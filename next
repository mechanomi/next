#!/usr/bin/env python3

import os
import sys
import json
import operator
import datetime
import random
import copy

from os.path import expanduser

from pprint import pprint # @@ remove

import numpy

from lxml import etree

CONFIG_FILE = ".next.xml"

DEBUG_RUNS = 50000

def get_config():
    home = expanduser("~")
    config_file = os.path.join(home, CONFIG_FILE)
    return etree.parse(config_file)

def parse_arg(arg):
    tokens = arg.split()
    flags, xpaths, inc_tags, ex_tags = [], [], [], []
    for token in tokens:
        if token.startswith(":"):
            flags.append(token[1:])
            continue
        if token.startswith("/"):
            xpaths.append(token)
            continue
        if token.startswith("-"):
            ex_tags.append(token[1:])
            continue
        if token.startswith("+"):
            inc_tags.append(token[1:])
            continue
    if not xpaths:
        xpaths.append("/*")
    return flags, xpaths, inc_tags, ex_tags

def carry_attrs(node):
    children = list(node)
    if not children:
        return
    parent_weight = float(node.get("weight", "1"))
    parent_tags = set(node.get("tags", "").split())
    total_weight = 0
    for child in children:
        if not isinstance(child.tag, str):
            continue
        total_weight += float(child.get("weight", "1"))
    for child in children:
        if not isinstance(child.tag, str):
            continue
        weight = float(child.get("weight", "1"))
        weight = weight / total_weight
        weight = str(parent_weight * weight)
        tags = set(child.get("tags", "").split())
        tags = " ".join((parent_tags.union(tags)))
        child.set("weight", weight)
        child.set("tags", tags)
        carry_attrs(child)

def get_tags(nodes):
    distinct_tags = set()
    for node in nodes:
        tags = node.get("tags").split()
        distinct_tags = distinct_tags.union(tags)
    return sorted(distinct_tags)

def match_tags(tags, inc_tags, ex_tags):
    match = True
    for inc_tag in inc_tags:
        if inc_tag not in tags:
            match = False
    for ex_tag in ex_tags:
        if ex_tag in tags:
            match = False
    return match

def process_tags(root, *args):
    selected_nodes = root.xpath("//*[@selected=1]")
    for node in selected_nodes:
        matched = match_tags(node.get("tags"), *args)
        if not matched:
            node.set("selected", "0")

def process_xpaths(root, xpaths):
    leaves = []
    for xpath in xpaths:
        for node in root.xpath(xpath):
            leaves += node.xpath("descendant::*[not(*)]")
    for leaf in leaves:
        leaf.set("selected", "1")

def choose(root):
    nodes = root.xpath("//*[not(*)][@selected=1]")
    if not nodes:
        print("Nothing to choose from")
        return
    weights = [float(node.get("weight")) for node in nodes]
    cum_weights = numpy.cumsum(weights)
    rand_weight = random.random() * numpy.sum(weights)
    i = numpy.searchsorted(cum_weights, rand_weight)
    return nodes[i]

def repr_node(node):
    tree = node.getroottree()
    return tree.getpath(node)

def stats(root):
    counts = {}
    for i in range(0, DEBUG_RUNS):
        node = choose(root)
        if node is None:
            break
        node_repr = repr_node(node)
        count = counts.get(node_repr, 0)
        counts[node_repr] = count + 1
    if not counts:
        return
    items = sorted(counts.items(), key=lambda x: x[1])
    items.reverse()
    output = []
    for item in items:
        pct = round((item[1] / DEBUG_RUNS) * 100)
        output.append("%s (%s%%)" % (item[0], pct))
    print("\n".join(output))

def tags(root):
    nodes = root.xpath("//*[not(*)][@selected=1]")
    output = get_tags(nodes)
    print(", ".join(output))

def main(arg):
    arg = " ".join(arg[1:]) # @@ fixme
    root = get_config().getroot()
    flags, xpaths, inc_tags, ex_tags = parse_arg(arg)
    carry_attrs(root)
    process_xpaths(root, xpaths)
    process_tags(root, inc_tags, ex_tags)
    # print(etree.tostring(root, pretty_print=True).decode("utf8"))
    if "stats" in flags:
        return stats(root)
    if "tags" in flags:
        return tags(root)
    if flags:
        print("Unrecognised flag")
        return
    node = choose(root)
    print(repr_node(node))

if __name__ == "__main__":
    main(sys.argv)
